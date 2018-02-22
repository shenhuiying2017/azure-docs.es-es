---
title: "Configurar la recuperación ante desastres para máquinas virtuales de VMware locales en Azure con Azure Site Recovery | Microsoft Docs"
description: "Aprenda a configurar la recuperación ante desastres para máquinas virtuales de VMware locales en Azure con el servicio Azure Site Recovery."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/15/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 3d9248d2501c7fea0492bad2687b6bdfb0b903e8
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/14/2018
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms"></a>Configurar la recuperación ante desastres para máquinas virtuales de VMware locales en Azure

Este tutorial muestra cómo configurar la recuperación ante desastres para máquinas virtuales de VMware locales con Windows en Azure. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Especificar el origen y destino de la replicación.
> * Configurar el entorno de replicación de origen, incluidos los componentes de Site Recovery local y el entorno de replicación de destino.
> * Creación de una directiva de replicación
> * Habilitación de la replicación para una máquina virtual

Este es el tercer tutorial de una serie. En este tutorial se da por hecho que ya ha realizado las tareas de los tutoriales anteriores:

1. [Preparación de Azure](tutorial-prepare-azure.md)
2. [Preparación de VMware local](tutorial-prepare-on-premises-vmware.md)

Antes de empezar, es útil [revisar la arquitectura](concepts-vmware-to-azure-architecture.md) del escenario de recuperación ante desastres.


## <a name="select-a-replication-goal"></a>Selección de un objetivo de replicación

1. En **Almacenes de Recovery Services**, haga clic en el nombre del almacén y **ContosoVMVault**.
2. En **Introducción**, haga clic en Site Recovery. Haga clic en **Preparar infraestructura**.
3. En **Objetivo de protección** > **¿Dónde están ubicadas las máquinas?**, seleccione **Local**.
4. En **¿A dónde quiere replicar las máquinas?**, seleccione **En Azure**.
5. En **¿Las máquinas están virtualizadas?**, seleccione **Yes, with VMware vSphere Hypervisor** (Sí, con VMware vSphere Hypervisor). A continuación, haga clic en **Aceptar**.

## <a name="set-up-the-source-environment"></a>Configuración del entorno de origen

Para configurar el entorno de origen es necesaria una máquina local, única y de alta disponibilidad que hospede los componentes de Site Recovery locales. Los componentes incluyen el servidor de configuración, el servidor de procesos y el servidor de destino maestro.

- El servidor de configuración coordina la comunicación entre el entorno local y Azure, además de administrar la replicación de datos.
- El servidor de procesos actúa como puerta de enlace de replicación. Recibe datos de replicación, los optimiza con almacenamiento en caché, compresión y cifrado y los envía al almacenamiento de Azure. El servidor de procesos también instala Mobility Service en las máquinas virtuales que quiere replicar, además de realizar la detección automática de las máquinas virtuales de VMware locales.
- El servidor de destino maestro controla los datos de replicación durante la conmutación por recuperación desde Azure.

Para configurar el servidor de configuración como máquina virtual de VMware de alta disponibilidad, descargue una plantilla de OVF preparada e impórtela a VMware para crear la máquina virtual. Después de configurar el servidor de configuración, regístrelo en el almacén. Tras el registro, Site Recovery detecta las máquinas virtuales locales de VMware.

### <a name="download-the-vm-template"></a>Descarga de la plantilla de máquina virtual

1. En el almacén, vaya a **Preparar infraestructura** > **Origen**.
2. En **Preparar origen**, haga clic en **+Servidor de configuración**.
3. En **Agregar servidor**, compruebe que aparezca **Servidor de configuración para VMware** en **Tipo de servidor**.
4. Descargue la plantilla de Open Virtualization Format (OVF) para el servidor de configuración.

  > [!TIP]
  La versión más reciente del servidor de configuración se puede descargar directamente desde el [Centro de descarga de Microsoft](https://aka.ms/asrconfigurationserver).

## <a name="import-the-template-in-vmware"></a>Importación de la plantilla en VMware

1. Inicie sesión en el servidor VMware vCenter o el host vSphere ESXi con el cliente de VMWare vSphere.
2. En el menú **File** (Archivo), seleccione **Deploy OVF Template** (Implementar plantilla de OVF) para iniciar el asistente para implementar plantillas de OVF.  

     ![Plantilla de OVF](./media/tutorial-vmware-to-azure/vcenter-wizard.png)

3. En **Select source** (Seleccionar origen), especifique la ubicación de la plantilla de OVF descargada.
4. En **Review details** (Revisar detalles), haga clic en **Next** (Siguiente).
5. En **Select name and folder** (Seleccionar nombre y la carpeta) y **Select configuration** (Seleccionar configuración), acepte la configuración predeterminada.
6. En **Select storage** (Seleccionar almacenamiento), seleccione **Thick Provision Eager Zeroed** (Aprovisionamiento grueso diligente con ceros) en **Select virtual disk format** (Seleccionar formato de disco virtual) para el mejor rendimiento.
4. En el resto de las páginas del asistente, acepte la configuración predeterminada.
5. En **Ready to complete** (Listo para completar):
  - Para configurar la máquina virtual con la configuración predeterminada, seleccione **Power on after deployment** > **Finish** (Encender después de la implementación > Finalizar).
  - Si desea agregar una interfaz de red adicional, desactive **Power on after deployment** (Encender después de la implementación) y seleccione **Finish** (Finalizar). De forma predeterminada, la plantilla del servidor de configuración se implementa con una sola NIC, pero puede agregar más después de la implementación.

  
## <a name="add-an-additional-adapter"></a>Incorporación de un adaptador adicional

Si desea agregar una NIC adicional al servidor de configuración, hágalo antes de registrar el servidor en el almacén. No se admiten adaptadores adicionales después del registro.

1. En el inventario de cliente de vSphere, haga clic con el botón derecho en la máquina virtual y seleccione **Editar configuración**.
2. En **Hardware**, haga clic en **Agregar** > **Adaptador Ethernet**. A continuación, haga clic en **Siguiente**.
3. Seleccione el tipo de adaptador y la red. 
4. Para conectar la NIC virtual al encender la máquina virtual, seleccione **Connect at power on** (Conectar al encender). Haga clic en **Siguiente** > **Finalizar** y en **Aceptar**.


## <a name="register-the-configuration-server"></a>Registro del servidor de configuración 

1. Desde la consola de cliente de VMWare vSphere, encienda la máquina virtual.
2. La máquina virtual se inicia en una experiencia de instalación de Windows Server 2016. Acepte el contrato de licencia y especifique una contraseña de administrador.
3. Una vez finalizada la instalación, inicie sesión en la máquina virtual como administrador.
4. La primera vez que inicie sesión, se inicia la herramienta de configuración de Azure Site Recovery.
5. Especifique el nombre que se usará para registrar el servidor de configuración en Site Recovery. A continuación, haga clic en **Siguiente**.
6. La herramienta comprueba que la máquina virtual pueda conectarse a Azure. Una vez establecida la conexión, haga clic en **Iniciar sesión** para iniciar sesión en la suscripción de Azure. Las credenciales deben tener acceso al almacén donde desea registrar el servidor de configuración.
7. La herramienta realiza algunas tareas de configuración y, a continuación, se reinicia.
8. Inicie sesión de nuevo en la máquina. El asistente para administración del servidor de configuración se iniciará automáticamente.

### <a name="configure-settings-and-connect-to-vmware"></a>Valores de configuración y conexión a VMware

1. En el asistente para administración del servidor de configuración > **Configurar conectividad**, seleccione la NIC que va a recibir tráfico de replicación. A continuación, haga clic en **Guardar**. Una vez configurado, este valor no se puede cambiar.
2. En **Seleccionar almacén de Recovery Services**, seleccione la suscripción de Azure y el grupo de recursos y almacén correspondientes.
3. En **Instalar software de terceros**, acepte el acuerdo de licencia y haga clic en **Descargar e instalar** para instalar MySQL Server.
4. Haga clic en **Install VMware PowerCLI** (Instalar VMware PowerCLI). Asegúrese de que todas las ventanas del explorador están cerradas antes. A continuación, haga clic en **Continue** (Continuar)
5. En **Validate appliance configuration** (Comprobar configuración del dispositivo), se comprobarán los requisitos previos antes de continuar.
6. En **Configure vCenter Server/vSphere ESXi server** (Configurar vCenter Server/vSphere ESXi Server), especifique la dirección IP o FQDN del servidor vCenter o host de vSphere en los se encuentran las máquinas virtuales donde desea realizar la replicación. Especifique el puerto de escucha del servidor y un nombre descriptivo para el servidor de VMware en el almacén.
7. Especifique las credenciales que usará el servidor de configuración para conectarse al servidor de VMware. Site Recovery usa estas credenciales para detectar automáticamente las máquinas virtuales de VMware disponibles para la replicación. Haga clic en **Add** (Agregar) y en **Continue** (Continuar).
8. En **Configure virtual machine credentials** (Configurar las credenciales de la máquina virtual), especifique el nombre de usuario y la contraseña que se usarán para instalar automáticamente Mobility Service en las máquinas (cuando se habilite la replicación). Para las máquinas Windows, la cuenta necesita privilegios de administrador local en las máquinas que se vayan a replicar. Para Linux, proporcione los detalles de la cuenta raíz.
9. Haga clic en **Finalize configuration** (Terminar configuración) para completar el registro. 
10. Cuando finalice el registro, en Azure Portal, verifique que el servidor de configuración y el servidor de VMware aparecen en la página **Origen** del almacén. Haga clic en **Aceptar** para configurar el destino.


Site Recovery se conecta a los servidores de VMware con la configuración especificada y detecta máquinas virtuales.

> [!NOTE]
> El nombre de la cuenta podría tardar 15 minutos o más en aparecer en el portal. Para que se actualice inmediatamente, haga clic en **Servidores de configuración** > ***nombre de servidor*** > **Actualizar servidor**.

## <a name="set-up-the-target-environment"></a>Configuración del entorno de destino

Seleccione y compruebe los recursos de destino.

1. Haga clic en **Preparar infraestructura** > **Destino** y seleccione la suscripción de Azure que quiere usar.
2. Especifique si el modelo de implementación de destino está basado en Resource Manager o si es el clásico.
3. Site Recovery comprueba que tiene una o más redes y cuentas de Azure Storage compatibles.

   ![Destino](./media/tutorial-vmware-to-azure/storage-network.png)

## <a name="create-a-replication-policy"></a>Creación de una directiva de replicación

1. Abra [Azure Portal](https://portal.azure.com) y haga clic en **Todos los recursos**.
2. Haga clic en el almacén de Recovery Service denominado **ContosoVMVault**.
3. Para crear una directiva de replicación, haga clic en **Infraestructura de Site Recovery** > **Directivas de replicación** > **+Directiva de replicación**.
4. En **Crear la directiva de replicación**, especifique un nombre de directiva **VMwareRepPolicy**.
5. En **Umbral de RPO**, use el valor predeterminado de 60 minutos. Este valor define la frecuencia de creación de puntos de recuperación. Se genera una alerta cuando la replicación continua supera este límite.
6. En **Retención de punto de recuperación**, use el valor predeterminado de 24 horas como duración del período de retención de cada punto de recuperación. En este tutorial se selecciona 72 horas. Las máquinas virtuales replicadas se pueden recuperar a cualquier momento de un período.
7. En **Frecuencia de instantánea coherente con la aplicación**, use el valor predeterminado de 60 minutos como frecuencia de creación de instantáneas coherentes con la aplicación. Haga clic en **Aceptar** para crear la directiva.

   ![Directiva](./media/tutorial-vmware-to-azure/replication-policy.png)

La directiva se asocia automáticamente al servidor de configuración. De forma predeterminada, se crea automáticamente una directiva correspondiente para la conmutación por recuperación. Por ejemplo, si la directiva de replicación es **rep-policy**, la directiva de conmutación por recuperación será **rep-policy-failback**. Esta directiva no se usa hasta que se inicie una conmutación por recuperación desde Azure.

## <a name="enable-replication"></a>Habilitar replicación

Site Recovery instala Mobility Service cuando se habilita la replicación para una máquina virtual. Los cambios pueden tardar 15 minutos o más en aplicarse y aparecer en el portal.

Habilite la replicación como se indica a continuación:

1. Haga clic en **Replicar la aplicación** > **Origen**.
2. En **Origen**, seleccione el servidor de configuración.
3. En **Tipo de máquina**, seleccione **Máquinas virtuales**.
4. En **vCenter/vSphere Hypervisor**, seleccione el servidor vCenter que administra el host de vSphere, o bien seleccione el host.
5. Seleccione el servidor de procesos (servidor de configuración). Luego haga clic en **Aceptar**.
6. En **Destino**, seleccione la suscripción y el grupo de recursos donde desee crear las máquinas virtuales conmutadas por error. Elija el modelo de implementación que desee usar en Azure (clásico o de Resource Manager) para las máquinas virtuales conmutadas por error.
7. Seleccione la cuenta de Azure Storage que desea usar para los datos de replicación.
8. Seleccione la red y la subred de Azure a la que se conectarán las máquinas virtuales de Azure cuando se creen después de la conmutación por error.
9. Seleccione la opción **Configurar ahora para las máquinas seleccionadas** con el fin de aplicar la configuración de red a todas las máquinas que seleccione para su protección. Seleccione **Configurar más tarde** para seleccionar la red de Azure por máquina.
10. En **Máquinas virtuales** > **Seleccionar máquinas virtuales**, haga clic en cada máquina que desea replicar y selecciónela. Solo puede seleccionar aquellas máquinas en las que se pueda habilitar la replicación. A continuación, haga clic en **Aceptar**.
11. En **Propiedades** > **Configurar propiedades**, seleccione la cuenta que va a usar el servidor de procesos para instalar automáticamente Mobility Service en el equipo.
12. En **Configuración de la replicación** > **Establecer configuración de replicación**, compruebe que se ha seleccionado la directiva de replicación correcta.
13. Haga clic en **Enable Replication**.

Puede hacer un seguimiento del progreso del trabajo **Habilitar protección** en **Configuración** > **Trabajos** > **Trabajos de Site Recovery**. La máquina estará preparada para la conmutación por error después de que finalice el trabajo **Finalizar la protección** .

Para supervisar las máquinas virtuales que agregue, puede comprobar la última hora de detección de máquinas virtuales en **Servidores de configuración**
> **Último contacto**. Para agregar máquinas virtuales sin esperar a la detección programada, resalte el servidor de configuración (no haga clic en él) y haga clic en **Actualizar**.

## <a name="next-steps"></a>pasos siguientes

> [!div class="nextstepaction"]
> [Exploración de la recuperación ante desastres](site-recovery-test-failover-to-azure.md)
