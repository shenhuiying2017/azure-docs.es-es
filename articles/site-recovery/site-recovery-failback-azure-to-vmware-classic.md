---
title: "Conmutación por recuperación de máquinas virtuales de VMware desde Azure en el portal clásico | Microsoft Docs"
description: "Aprenda a conmutar por recuperación al sitio local después de conmutar por error las máquinas virtuales de VMware y los servidores físicos a Azure."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: mkjain
editor: 
ms.assetid: 7ca86e21-7a5d-45ab-8f4b-e42da90f199a
ms.service: site-recovery
ms.devlang: na
ms.tgt_pltfrm: na
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 06/05/2017
ms.author: ruturajd
ms.openlocfilehash: 82d5eb7fd13b1e9700a3e9bc2d30775e9c129749
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="fail-back-vmware-virtual-machines-and-physical-servers-to-the-on-premises-site-classic-portal"></a>Conmutación por recuperación de máquinas virtuales de VMware y servidores físicos en el sitio local (Portal clásico)
> [!div class="op_single_selector"]
> * [Portal de Azure](site-recovery-failback-azure-to-vmware.md)
> * [Portal de Azure clásico](site-recovery-failback-azure-to-vmware-classic.md)
> * [Portal de Azure clásico (heredado)](site-recovery-failback-azure-to-vmware-classic-legacy.md)
>
>

En este artículo se describe cómo conmutar por recuperación máquinas virtuales de Azure al sitio local. Siga las instrucciones que se describen en este artículo cuando esté listo para conmutar por recuperación máquinas virtuales de VMware o servidores físicos de Windows o Linux después de que han conmutado por error desde el sitio local a Azure mediante este [tutorial](site-recovery-vmware-to-azure-classic.md).

## <a name="overview"></a>Información general
Este diagrama muestra la arquitectura de conmutación por recuperación en este escenario.

Use esta arquitectura cuando el servidor de procesos sea local y se esté usando ExpressRoute.

![](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)

Use esta arquitectura cuando el servidor de procesos esté en Azure y tenga una VPN o una conexión de ExpressRoute.

![](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)

Para ver la lista completa de puertos y el diagrama de la arquitectura de la conmutación por recuperación, consulte la imagen siguiente.

![](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

Así es cómo funciona la conmutación por recuperación:

* Después de conmutar por error a Azure, conmuta por recuperación a su sitio local en unas cuantas fases:
  * **Fase 1**: proteja de nuevo las máquinas virtuales de Azure de modo que comiencen a replicarse otra vez en las de VMware que se ejecutan en el sitio local. Al habilitar la reprotección, la máquina virtual se mueve a un grupo de protección de conmutación por recuperación que se crea automáticamente cuando crea al principio el grupo de protección de conmutación por error. Si agregó el grupo de protección de conmutación por error a un plan de recuperación, el grupo de protección de conmutación por recuperación también se agregó automáticamente al plan.  Durante la reprotección, especifica cómo planear la conmutación por recuperación.
  * **Fase 2**: una vez que las máquinas virtuales de Azure se estén replicando en el sitio local, ejecute una conmutación por error para conmutar por recuperación desde Azure.
  * **Fase 3**: después de que los datos se hayan conmutado por recuperación, vuelva a proteger las máquinas virtuales locales a las que conmutó por recuperación, para que comiencen a replicarse en Azure.


  [!VIDEO https://channel9.msdn.com/Blogs/Azure/Enhanced-VMware-to-Azure-Failback/player]

### <a name="failback-to-the-original-or-alternate-location"></a>Conmutación por recuperación a la ubicación original o alternativa
Si ha conmutado por error una máquina virtual de VMware, puede conmutar por recuperación a la misma máquina virtual de origen si aún existe en el entorno local. En este escenario solo se conmutarán por recuperación los cambios diferenciales. Observe lo siguiente:

* Si ha conmutado por error servidores físicos, la conmutación por recuperación siempre es a una nueva máquina virtual de VMware.
  * Antes de conmutar por recuperación una máquina física, tenga en cuenta lo siguiente:
  * La máquina física protegida volverá como una virtual cuando se conmute por recuperación de Azure a VMware.
  * Asegúrese de que detectar al menos un servidor de destino maestro junto con los hosts ESX/ESXi necesarios que necesita para la conmutación por recuperación.
* Si conmuta por recuperación a la máquina virtual original, se necesita lo siguiente:

  * Si la máquina virtual está administrada por un servidor vCenter, el host ESX del destino principal debe tener acceso al almacén de datos de máquinas virtuales.
  * Si la máquina virtual está en un host ESX, pero no está administrada por vCenter, el disco duro de la máquina virtual debe estar en un almacén de datos accesible para el host de MT.
  * Si la máquina virtual está en un host ESX y no usa vCenter, debe completar la detección del host ESX de MT antes de volver a protegerla. Lo mismo se aplica si también conmuta por recuperación a servidores físicos.
  * Otra opción (si existe la máquina virtual local) es eliminarla antes de realizar una conmutación por recuperación. En este caso, la conmutación por recuperación crea una nueva máquina virtual en el mismo host que el host ESX de destino maestro.
* Cuando se conmuta por recuperación a una ubicación alternativa, los datos se recuperan en el mismo almacén de datos y el mismo host ESX que los usados por el servidor de destino maestro local.

## <a name="prerequisites"></a>Requisitos previos
* Necesitará un entorno de VMware para conmutar por recuperación máquinas virtuales de VMware y servidores físicos. No se admite la conmutación por recuperación a un servidor físico.
* Para realizar la conmutación por recuperación , debe haber creado una red de Azure cuando configuró inicialmente la protección. La conmutación por recuperación necesita una conexión VPN o ExpressRoute desde la red de Azure, en la que se encuentran las máquinas virtuales de Azure, hasta el sitio local.
* Si las máquinas virtuales que desea conmutar por recuperación las administra un servidor vCenter, deberá asegurarse de tener los permisos necesarios para la detección de máquinas virtuales en servidores vCenter. [Más información](site-recovery-vmware-to-azure-classic.md).
* Si existen instantáneas en una máquina virtual, la reprotección dará error. Puede eliminar las instantáneas o los discos.
* Antes de conmutar por recuperación, deberá crear una serie de componentes:
  * **Cree un servidor de procesos en Azure**. Es una máquina virtual de Azure que necesitará crear y mantener en ejecución durante la conmutación por recuperación. Puede eliminar la máquina una vez completada la operación.
  * **Cree un servidor de destino maestro**: este servidor envía y recibe datos de conmutación por recuperación. El servidor de administración que creó de forma local tiene instalado de forma predeterminada un servidor de destino maestro. Sin embargo, según el volumen de tráfico conmutado por recuperación, puede que deba crear un servidor de destino maestro distinto para esta operación.
  * Si desea crear un servidor de destino maestro adicional que se ejecute en Linux, debe configurar la máquina virtual de Linux antes de instalar al servidor de destino maestro, como se describe a continuación.
* El servidor de configuración es necesario localmente cuando realiza una conmutación por recuperación. Durante la conmutación por recuperación, la máquina virtual debe encontrarse en la base de datos del servidor de configuración. De lo contrario, la conmutación por recuperación no se realizará correctamente. Por lo tanto, asegúrese de realizar una copia de seguridad programada periódica del servidor. En caso de desastre, tendrá que restaurarla con la misma dirección IP para que funcione la conmutación por recuperación.

## <a name="set-up-the-process-server-in-azure"></a>Configuración del servidor de procesos en Azure
Para que las máquinas virtuales de Azure puedan devolver los datos a un servidor de destino maestro local, es preciso instalar un servidor de procesos en Azure.

1. En el portal de Site Recovery > **Servidores de configuración**, seleccione esta opción para agregar un nuevo servidor de procesos.

   ![](./media/site-recovery-failback-azure-to-vmware-classic/ps1.png)
2. Especifique un nombre de servidor de procesos y escriba un nombre y la contraseña que se utilizará para conectarse a la máquina virtual de Azure como administrador. En **Servidor de configuración** , seleccione el servidor de administración local y especifique la red de Azure en la que se debe implementar el servidor de procesos. Debe ser la red en la que se encuentran las máquinas virtuales de Azure. Especifique una dirección IP única de la subred seleccionada y comience la implementación.

   ![](./media/site-recovery-failback-azure-to-vmware-classic/ps2.png)

   Se desencadenará un trabajo para implementar el servidor de procesos.

   ![](./media/site-recovery-failback-azure-to-vmware-classic/ps3.png)

   Después de que el servidor de procesos se implemente en Azure, podrá iniciar sesión en él con las credenciales que especificó. La primera vez que inicia sesión, se ejecutará el cuadro de diálogo del servidor de procesos. Escriba la dirección IP del servidor de administración local y su frase de contraseña. Deje la configuración predeterminada del puerto 443. También puede dejar el puerto predeterminado 9443 para la replicación de datos, a no ser que modificara en concreto este valor al configurar el servidor de administración local.

   > [!NOTE]
   > El servidor no estará visible en las **propiedades de la máquina virtual**. Solo aparecerá en la pestaña **Servidores** del servidor de administración en el que se ha registrado. Puede tardar entre 10 y 15 minutos en aparecer el servidor de procesos.
   >
   >

## <a name="set-up-the-master-target-server-on-premises"></a>Configuración del servidor de destino maestro local
El servidor de destino maestro recibe los datos de conmutación por recuperación. Un servidor de destino maestro se instala automáticamente en el servidor de administración local, pero si va a conmutar por recuperación muchos datos puede que tenga que configurar otro adicional. Para ello, realice lo siguiente:

> [!NOTE]
> Si desea instalar un servidor de destino maestro en Linux, siga las instrucciones del procedimiento siguiente.
>
>

1. Si va a instalar al servidor de destino maestro en Windows, abra la página de inicio rápido de la máquina virtual en la que va a instalarlo y descargue el archivo de instalación para el Asistente de configuración unificada de Azure Site Recovery.
2. Ejecute el programa de instalación y, en **Antes de comenzar**, seleccione **Add additional process servers to scale out deployment** (Agregar servidores de procesos adicionales para el escalado horizontal de la implementación).
3. Complete el asistente tal y como hizo cuando [instaló el servidor de administración](site-recovery-vmware-to-azure-classic.md). En la página **Configuration Server Details** (Detalles del servidor de configuración), especifique la dirección IP de este servidor de destino maestro y una frase de contraseña para acceder a la máquina virtual.

### <a name="set-up-a-linux-vm-as-the-master-target-server"></a>Configuración de una máquina virtual de Linux como servidor de destino maestro
Para configurar el servidor de administración que ejecuta el servidor de destino maestro como una máquina virtual de Linux, será necesario que instale el sistema operativo mínimo CentOS 6.6, que recupere los id. SCSI de cada disco duro SCSI, que instale algunos paquetes adicionales y que aplique algunos cambios personalizados.

#### <a name="install-centos-66"></a>Instalación de CentOS 6.6
1. Instale el sistema operativo mínimo CentOS 6.6 en la máquina virtual del servidor de administración. Mantenga la imagen ISO en una unidad de DVD y arranque el sistema. Omita la comprobación de medios, seleccione inglés de Estados Unidos como el idioma, elija **Basic Storage Devices** (Dispositivos de almacenamiento básico), compruebe que el disco duro no tenga información importante y haga clic en **Sí** para descartar los datos. Escriba el nombre de host del servidor de administración y seleccione el adaptador de red del servidor.  En el cuadro de diálogo **Editing System** (Sistema de edición), seleccione **Conectar automáticamente** y agregue una dirección IP estática, una red y parámetros de configuración de DNS. Especifique una zona horaria y una contraseña raíz para acceder al servidor de administración.
2. Cuando se le pregunte por el tipo de instalación que quiere, seleccione **Create Custom Layout** (Crear diseño personalizado) como partición. Tras hacer clic en **Siguiente** select **Gratis** y haga clic en Crear. Cree **/**,  **/var/crash** y **/home partitions** con **FS Type:** **ext4**. Cree la partición de intercambio como **FS Type: swap**(Tipo FS: intercambio).
3. Si se encuentran dispositivos ya existentes, aparecerá un mensaje de advertencia. Haga clic en **Formato** para formatear la unidad con la configuración de partición. Haga clic en **Write change to disk** (Escribir cambios en el disco) para aplicar los cambios de partición.
4. Seleccione **Install boot loader (Instalar cargador de arranque)** > **Siguiente** para instalar el cargador de arranque en la partición raíz.
5. Cuando la instalación se haya completado, haga clic en **Reiniciar**.

#### <a name="retrieve-the-scsi-ids"></a>Recuperación de los id. SCSI
1. Después de la instalación, recupere los id. SCSI de cada disco duro SCSI de la máquina virtual. Para ello, cierre la máquina virtual del servidor de administración y, en las propiedades de la máquina virtual en VMware, haga clic con el botón derecho en la entrada de la máquina virtual > **Editar configuración** > **Opciones**.
2. Seleccione **Avanzadas** > **General item** (Elemento general) y haga clic en **Configuration Parameters** (Parámetros de configuración). Esta opción estará desactivada cuando se ejecuta la máquina. Para que se active, la máquina debe estar apagada.
3. Si existe la fila **disk.EnableUUID**, asegúrese de que el valor esté establecido en **True** (distingue mayúsculas de minúsculas). En este caso, puede cancelar y probar el comando SCSI en un sistema operativo invitado después de arrancar.
4. Si la fila no existe, haga clic en **Agregar fila** y agréguela con el valor **True**. No utilice comillas dobles.

#### <a name="install-additional-packages"></a>Instalación de paquetes adicionales
Debe descargar e instalar algunos paquetes adicionales.

1. Asegúrese de que el servidor de destino maestro esté conectado a Internet.
2. Ejecute este comando para descargar e instalar 15 paquetes desde el repositorio de CentOS: **# yum install –y xfsprogs perl lsscsi rsync wget kexec-tools**.
3. Si las máquinas de origen que va a proteger ejecutan Linux con el sistema de archivos Reiser o XFS en el dispositivo raíz o de arranque, debe descargar e instalar paquetes adicionales de la manera siguiente:

   * # <a name="cd-usrlocal"></a># cd /usr/local
   * # <a name="wget-httpelrepoorglinuxelrepoel6x8664rpmskmod-reiserfs-00-1el6elrepox8664rpmhttpelrepoorglinuxelrepoel6x8664rpmskmod-reiserfs-00-1el6elrepox8664rpm"></a>wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm)
   * # <a name="wget-httpelrepoorglinuxelrepoel6x8664rpmsreiserfs-utils-3621-1el6elrepox8664rpmhttpelrepoorglinuxelrepoel6x8664rpmsreiserfs-utils-3621-1el6elrepox8664rpm"></a>wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm)
   * # <a name="rpm-ivh-kmod-reiserfs-00-1el6elrepox8664rpm-reiserfs-utils-3621-1el6elrepox8664rpm"></a>rpm –ivh kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm
   * # <a name="wget-httpmirrorcentosorgcentos66osx8664packagesxfsprogs-311-16el6x8665rpmhttpmirrorcentosorgcentos66osx8664packagesxfsprogs-311-16el6x8665rpm"></a>wget [http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm](http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm)
   * # <a name="rpm-ivh-xfsprogs-311-16el6x8664rpm"></a>rpm –ivh xfsprogs-3.1.1-16.el6.x86_64.rpm

#### <a name="apply-custom-changes"></a>Aplicación de cambios personalizados
Lleve a cabo los siguientes pasos para aplicar cambios personalizados después de haber completado los pasos posteriores a la instalación y haber instalado los paquetes:

1. Copie al binario de RHEL 6-64 Unified Agent en la máquina virtual. Ejecute este comando para descomprimir el archivo binario: **tar –zxvf <file name>**
2. Ejecute este comando para conceder permisos: **# chmod 755 ./ApplyCustomChanges.sh**
3. Ejecute el script: **# ./ApplyCustomChanges.sh**. Solo debe ejecutar el script una vez. Después de que el script se haya ejecutado correctamente, reinicie el servidor.

## <a name="run-the-failback"></a>Ejecución de la conmutación por recuperación
### <a name="reprotect-the-azure-vms"></a>Reprotección de las máquinas virtuales de Azure
1. En el portal de Site Recovery > pestaña **Máquinas**, seleccione la máquina virtual que se haya conmutado por error y haga clic en **Volver a proteger**.
2. En **Servidor de destino maestro** y **Servidor de procesos**, seleccione el servidor de destino maestro local y el servidor de procesos de la máquina virtual de Azure.
3. Seleccione la cuenta configurada para conectarse a la máquina virtual.
4. Seleccione la versión de la conmutación por recuperación del grupo de protección. Por ejemplo, si la máquina virtual está protegida en PG1, deberá seleccionar PG1_Failback.
5. Si desea recuperar en una ubicación alternativa, seleccione la unidad de retención y el almacén de datos configurado para el servidor de destino maestro. Cuando se conmuta por recuperación al sitio local, las máquinas virtuales de VMware del plan de protección de conmutación por recuperación utilizarán el mismo almacén de datos que el servidor de destino maestro. Si desea recuperar la réplica de la máquina virtual de Azure en la misma máquina virtual local, la máquina virtual local ya debe estar en el mismo almacén de datos que el servidor de destino maestro. Si no hay ninguna máquina virtual local, se creará una nueva durante la reprotección.

   ![](./media/site-recovery-failback-azure-to-vmware-classic/failback1.png)
6. Tras hacer clic en **Aceptar** para comenzar la reprotección, comienza un trabajo para replicar la máquina virtual de Azure en el sitio local. Puede realizar el seguimiento del progreso en la pestaña **Trabajos** .

   ![](./media/site-recovery-failback-azure-to-vmware-classic/failback2.png)

### <a name="run-a-failover-to-the-on-premises-site"></a>Ejecución de una conmutación por error en el sitio local
Tras la reprotección, la máquina virtual se mueve a la versión de conmutación por recuperación de su grupo de protección y se agrega automáticamente al plan de recuperación usado para la conmutación por error a Azure, si es que existe.

1. En la página **Planes de recuperación**, seleccione el plan de recuperación que contenga el grupo de conmutación por recuperación y haga clic en **Conmutación por error** > **Conmutación por error no planeada**.
2. En **Confirmar conmutación por error** , compruebe la dirección de conmutación por error (a Azure) y seleccione el punto de recuperación que quiere usar para esta (el último). Si habilitó **Multi-VM** (Varias máquinas virtuales) al configurar las propiedades de replicación, puede realizar la recuperación al último punto de recuperación coherente con el bloqueo o la aplicación. Haga clic en la marca de verificación para iniciar la conmutación por error.
3. Durante la conmutación por error, se cerrarán las máquinas virtuales de Azure en Site Recovery. Después de comprobar que la conmutación por recuperación se ha completado como se esperaba, puede comprobar que las máquinas virtuales de Azure se han cerrado según lo previsto.

### <a name="reprotect-the--on-premises-site"></a>Volver a proteger el sitio local
Después de completarse la conmutación por recuperación, los datos vuelven al sitio local, pero no están protegidos. Para volver a iniciar la replicación en Azure, vuelva a hacer lo siguiente:

1. En el portal de Site Recovery > pestaña **Máquinas**, seleccione las máquinas virtuales que se han conmutado por recuperación y haga clic en **Volver a proteger**.
2. Después de comprobar que la replicación en Azure funciona como se esperaba, puede eliminar en Azure las máquinas virtuales de Azure (que no se ejecutan actualmente) que se conmutaron por recuperación.

### <a name="common-issues-in-failback"></a>Problemas comunes en la conmutación por recuperación
1. Si realiza la detección de usuarios de solo lectura de vCenter y protege las máquinas virtuales, se ejecutará correctamente y la conmutación por error funcionará. En el momento de la reprotección, generará un error, ya que no se podrán detectar los almacenes de datos. Como síntoma, no verá los almacenes de datos enumerados mientras se esté volviendo a proteger. Para resolver este problema, puede actualizar las credenciales de vCenter con la cuenta adecuada que tenga permisos y tratar de realizar el trabajo de nuevo. [Más información](site-recovery-vmware-to-azure-classic.md)
2. Cuando conmuta por recuperación una VM de Linux y la ejecuta localmente, verá que se ha desinstalado el paquete del administrador de red de la máquina. Esto se debe a que cuando se recupera la VM en Azure, se elimina el paquete del administrador de red.
3. Cuando una máquina virtual se configura con una dirección IP estática y se conmuta por error a Azure, se adquiere la dirección IP mediante DHCP. Al conmutar por recuperación a un entorno local, la VM seguirá utilizando DHCP para obtener la dirección IP. Tendrá que iniciar sesión manualmente en la máquina y configurar de nuevo la dirección IP como una estática, si así se requiere.
4. Si utiliza las ediciones gratuitas de ESXi 5.5 o vSphere Hypervisor 6, la conmutación por error se llevará a cabo correctamente, pero la conmutación por recuperación, no. Tendrá que actualizar el software con una licencia de evaluación para habilitar la conmutación por recuperación.

## <a name="failing-back-with-expressroute"></a>Conmutación por recuperación con ExpressRoute
Puede conmutar por recuperación a través de una conexión VPN o de Azure ExpressRoute. Si desea usar ExpressRoute, tenga en cuenta lo siguiente:

* ExpressRoute se debe configurar en la red virtual de Azure a la que conmutarán por error las máquinas de origen, y en la que se encuentran las máquinas virtuales de Azure después de que tiene lugar este proceso.
* Los datos se replican en una cuenta de almacenamiento de Azure en un punto de conexión público. Para usar ExpressRoute, debe realizar la configuración entre pares públicos en ExpressRoute con el centro de datos de destino para la replicación de Site Recovery.
