---
title: "Configurar la recuperación ante desastres para máquinas virtuales de VMware locales en Azure con Azure Site Recovery | Microsoft Docs"
description: "Aprenda a configurar la recuperación ante desastres para máquinas virtuales de VMware locales en Azure con Azure Site Recovery."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/15/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: cfb5e266a8454f1604e9e697100e89a5791368ab
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms"></a>Configurar la recuperación ante desastres para máquinas virtuales de VMware locales en Azure

Este tutorial muestra cómo configurar la recuperación ante desastres para máquinas virtuales de VMware locales con Windows en Azure. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Especificar el origen y destino de la replicación.
> * Configurar el entorno de replicación de origen, incluidos los componentes locales de Azure Site Recovery y el entorno de replicación de destino.
> * Crear una directiva de replicación.
> * Habilitar la replicación para una máquina virtual.

Este es el tercer tutorial de una serie. En este tutorial se da por hecho que ya ha realizado las tareas de los tutoriales anteriores:

* [Preparación de Azure](tutorial-prepare-azure.md)
* [Preparación de VMware local](tutorial-prepare-on-premises-vmware.md)

Antes de empezar, es útil [revisar la arquitectura](concepts-vmware-to-azure-architecture.md) de los escenarios de recuperación ante desastres.


## <a name="select-a-replication-goal"></a>Selección de un objetivo de replicación

1. En **Almacenes de Recovery Services**, haga clic en el nombre del almacén **ContosoVMVault**.
2. En **Introducción**, seleccione Site Recovery. Luego, seleccione **Preparar infraestructura**.
3. En **Objetivo de protección** > **¿Dónde están ubicadas las máquinas?**, seleccione **Local**.
4. En **¿A dónde quiere replicar las máquinas?**, seleccione **En Azure**.
5. En **¿Las máquinas están virtualizadas?**, seleccione **Yes, with VMware vSphere Hypervisor** (Sí, con VMware vSphere Hypervisor). Después seleccione **Aceptar**.

## <a name="set-up-the-source-environment"></a>Configuración del entorno de origen

Para configurar el entorno de origen, es necesaria una máquina local, única y de alta disponibilidad que hospede los componentes locales de Site Recovery. Los componentes son el servidor de configuración, el servidor de procesos y el servidor de destino maestro:

- El servidor de configuración coordina la comunicación entre el entorno local y Azure, además de administrar la replicación de datos.
- El servidor de procesos actúa como puerta de enlace de replicación. Recibe datos de replicación, los optimiza con almacenamiento en caché, compresión y cifrado, y los envía a Azure Storage. El servidor de procesos también instala Mobility Service en las máquinas virtuales que quiere replicar, además de realizar la detección automática de las máquinas virtuales de VMware locales.
- El servidor de destino maestro controla los datos de replicación durante la conmutación por recuperación desde Azure.

Para configurar el servidor de configuración como máquina virtual de VMware de alta disponibilidad, descargue una plantilla Open Virtualization Format (OVF) preparada e impórtela a VMware para crear la máquina virtual. Después de configurar el servidor de configuración, regístrelo en el almacén. Tras el registro, Site Recovery detecta las máquinas virtuales locales de VMware.

### <a name="download-the-vm-template"></a>Descarga de la plantilla de máquina virtual

1. En el almacén, vaya a **Preparar infraestructura** > **Origen**.
2. En **Preparar origen**, seleccione **+Servidor de configuración**.
3. En **Agregar servidor**, compruebe que aparezca **Servidor de configuración para VMware** en **Tipo de servidor**.
4. Descargue la plantilla OVF para el servidor de configuración.

  > [!TIP]
  Puede descargar la versión más reciente de la plantilla del servidor de configuración directamente desde el [Centro de descarga de Microsoft](https://aka.ms/asrconfigurationserver).

## <a name="import-the-template-in-vmware"></a>Importación de la plantilla en VMware

1. Inicie sesión en el servidor VMware vCenter o el host vSphere ESXi con el cliente de VMWare vSphere.
2. En el menú **File** (Archivo), seleccione **Deploy OVF Template** (Implementar plantilla OVF) para iniciar el asistente para implementar plantillas OVF. 

     ![Plantilla de OVF](./media/tutorial-vmware-to-azure/vcenter-wizard.png)

3. En **Select source** (Seleccionar origen), especifique la ubicación de la plantilla OVF descargada.
4. En **Review details** (Revisar detalles), seleccione **Next** (Siguiente).
5. En **Select name and folder** (Seleccionar el nombre y la carpeta) y **Select configuration** (Seleccionar la configuración), acepte la configuración predeterminada.
6. En **Select storage** (Seleccionar almacenamiento), seleccione **Thick Provision Eager Zeroed** (Aprovisionamiento grueso diligente con ceros) en **Select virtual disk format** (Seleccionar formato de disco virtual) para obtener un rendimiento mejor.
7. En el resto de las páginas del asistente, acepte la configuración predeterminada.
8. En **Ready to complete** (Listo para completar):

    * Para configurar la máquina virtual con la configuración predeterminada, seleccione **Power on after deployment** > **Finish** (Encender después de la implementación > Finalizar).

    * Si desea agregar una interfaz de red adicional, desactive **Power on after deployment** (Encender después de la implementación). A continuación, seleccione **Finish** (Finalizar). De forma predeterminada, la plantilla del servidor de configuración se implementa con una sola NIC. Puede agregar NIC adicionales después de la implementación.

## <a name="add-an-additional-adapter"></a>Incorporación de un adaptador adicional

Si desea agregar una NIC adicional al servidor de configuración, hágalo antes de registrar el servidor en el almacén. No se admiten adaptadores adicionales después del registro.

1. En el inventario de cliente de vSphere, haga clic con el botón derecho en la máquina virtual y seleccione **Editar configuración**.
2. En **Hardware**, seleccione **Agregar** > **Adaptador Ethernet**. Luego, seleccione **Siguiente**.
3. Seleccione el tipo de adaptador y la red. 
4. Para conectar la NIC virtual al encender la máquina virtual, seleccione **Connect at power on** (Conectar al encender). Seleccione **Siguiente** > **Finalizar**. Después seleccione **Aceptar**.


## <a name="register-the-configuration-server"></a>Registro del servidor de configuración 

1. Desde la consola de cliente de VMWare vSphere, encienda la máquina virtual.
2. La máquina virtual se inicia en una experiencia de instalación de Windows Server 2016. Acepte el contrato de licencia y especifique una contraseña de administrador.
3. Una vez finalizada la instalación, inicie sesión en la máquina virtual como administrador.
4. La primera vez que inicie sesión, se inicia la herramienta de configuración de Azure Site Recovery.
5. Especifique el nombre que se usará para registrar el servidor de configuración en Site Recovery. Luego, seleccione **Siguiente**.
6. La herramienta comprueba que la máquina virtual pueda conectarse a Azure. Una vez establecida la conexión, haga clic en **Iniciar sesión** para iniciar sesión en la suscripción de Azure. Las credenciales deben tener acceso al almacén donde desea registrar el servidor de configuración.
7. La herramienta realiza algunas tareas de configuración y, a continuación, se reinicia.
8. Inicie sesión de nuevo en la máquina. El asistente para administración del servidor de configuración se inicia automáticamente.

### <a name="configure-settings-and-connect-to-vmware"></a>Valores de configuración y conexión a VMware

1. En el asistente para administración del servidor de configuración, seleccione **Configurar conectividad** y seleccione la NIC que va a recibir el tráfico de replicación. Después, seleccione **Guardar**. Una vez configurada, esta opción no se puede cambiar.
2. En **Seleccionar almacén de Recovery Services**, seleccione la suscripción de Azure y el grupo de recursos y almacén correspondientes.
3. En **Instalar software de terceros**, acepte el contrato de licencia. Seleccione **Descargar e instalar** para instalar MySQL Server.
4. Seleccione **Install VMware PowerCLI** (Instalar VMware PowerCLI). Asegúrese de que todas las ventanas del explorador están cerradas antes. Después, seleccione **Continuar**.
5. En **Validate appliance configuration** (Comprobar configuración del dispositivo), se comprueban los requisitos previos antes de continuar.
6. En **Configure vCenter Server/vSphere ESXi server** (Configurar vCenter Server/vSphere ESXi Server), especifique la dirección IP o FQDN del servidor vCenter o host de vSphere en los que se encuentran las máquinas virtuales donde desea realizar la replicación. Especifique el puerto en el que escucha el servidor. Escriba un nombre descriptivo que se usará para el servidor de VMware en el almacén.
7. Especifique las credenciales que usará el servidor de configuración para conectarse al servidor de VMware. Site Recovery usa estas credenciales para detectar automáticamente las máquinas virtuales de VMware disponibles para la replicación. Seleccione **Agregar** y, luego, **Continuar**.
8. En **Configure virtual machine credentials** (Configurar las credenciales de la máquina virtual), especifique el nombre de usuario y la contraseña que se usarán para instalar automáticamente Mobility Service en las máquinas, cuando la replicación esté habilitada. Para las máquinas Windows, la cuenta necesita privilegios de administrador local en las máquinas que se vayan a replicar. Para Linux, proporcione los detalles de la cuenta raíz.
9. Seleccione **Finalize configuration** (Terminar configuración) para completar el registro. 
10. Cuando finalice el registro, en Azure Portal, verifique que el servidor de configuración y el servidor de VMware aparecen en la página **Origen** del almacén. Haga clic en **Aceptar** para configurar el destino.


Site Recovery se conecta a los servidores de VMware con la configuración especificada y detecta las máquinas virtuales.

> [!NOTE]
> El nombre de la cuenta podría tardar 15 minutos o más en aparecer en el portal. Para que se actualice inmediatamente, seleccione **Servidores de configuración** > ***nombre de servidor*** > **Actualizar servidor**.

## <a name="set-up-the-target-environment"></a>Configuración del entorno de destino

Seleccione y compruebe los recursos de destino.

1. Seleccione **Preparar infraestructura** > **Destino**. Seleccione la suscripción de Azure que quiere usar.
2. Especifique si el modelo de implementación de destino está basado en Azure Resource Manager o si es el modelo clásico.
3. Site Recovery comprueba que tiene una o más redes y cuentas de Azure Storage compatibles.

   ![Pestaña Destino](./media/tutorial-vmware-to-azure/storage-network.png)

## <a name="create-a-replication-policy"></a>Creación de una directiva de replicación

1. Abra [Azure Portal](https://portal.azure.com) y seleccione **Todos los recursos**.
2. Haga clic en el almacén de Recovery Service llamado **ContosoVMVault**.
3. Para crear una directiva de replicación, seleccione **Site Recovery Infrastructure (Infraestructura de Site Recovery)** > **Directivas de replicación** > **+Directiva de replicación**.
4. En **Crear la directiva de replicación**, especifique el nombre de directiva **VMwareRepPolicy**.
5. En **Umbral de RPO**, use el valor predeterminado de 60 minutos. Este valor define la frecuencia de creación de puntos de recuperación. Se genera una alerta cuando la replicación continua supera este límite.
6. En **Retención de punto de recuperación**, use el valor predeterminado de 24 horas como duración del período de retención de cada punto de recuperación. En este tutorial, use 72 horas. Las máquinas virtuales replicadas se pueden recuperar a cualquier momento de un período.
7. En **Frecuencia de instantánea coherente con la aplicación**, use el valor predeterminado de 60 minutos como frecuencia de creación de instantáneas coherentes con la aplicación. Seleccione **Aceptar** para crear la directiva.

   ![Creación de la directiva de replicación](./media/tutorial-vmware-to-azure/replication-policy.png)

La directiva se asocia automáticamente al servidor de configuración. De forma predeterminada, se crea automáticamente una directiva correspondiente para la conmutación por recuperación. Por ejemplo, si la directiva de replicación es **rep-policy**, la directiva de conmutación por recuperación será **rep-policy-failback**. Esta directiva no se usa hasta que se inicie una conmutación por recuperación desde Azure.

## <a name="enable-replication"></a>Habilitar replicación

Site Recovery instala Mobility Service cuando se habilita la replicación para una máquina virtual. Los cambios pueden tardar 15 minutos o más en aplicarse y aparecer en el portal.

Habilite la replicación como se indica a continuación:

1. Seleccione **Replicar la aplicación** > **Origen**.
2. En **Origen**, seleccione el servidor de configuración.
3. En **Tipo de máquina**, seleccione **Máquinas virtuales**.
4. En **vCenter/vSphere Hypervisor**, seleccione el servidor vCenter que administra el host de vSphere, o bien seleccione el host.
5. Seleccione el servidor de procesos (servidor de configuración). Después seleccione **Aceptar**.
6. En **Destino**, seleccione la suscripción y el grupo de recursos donde desee crear las máquinas virtuales conmutadas por error. Elija el modelo de implementación que quiera usar en Azure (clásico o de Resource Manager) para las máquinas virtuales conmutadas por error.
7. Seleccione la cuenta de Azure Storage que desea usar para los datos de replicación.
8. Seleccione la red y la subred de Azure a la que se conectarán las máquinas virtuales de Azure cuando se creen después de la conmutación por error.
9. Seleccione la opción **Configurar ahora para las máquinas seleccionadas** con el fin de aplicar la configuración de red a todas las máquinas que seleccione para su protección. Seleccione **Configurar más tarde** para seleccionar la red de Azure por máquina.
10. En **Máquinas virtuales** > **Seleccionar máquinas virtuales**, seleccione cada máquina que desea replicar. Solo puede seleccionar aquellas máquinas en las que se pueda habilitar la replicación. Después seleccione **Aceptar**.
11. En **Propiedades** > **Configurar propiedades**, seleccione la cuenta que usará el servidor de procesos para instalar automáticamente Mobility Service en la máquina.
12. En **Configuración de la replicación** > **Establecer configuración de replicación**, compruebe que se haya seleccionado la directiva de replicación correcta.
13. Seleccione **Habilitar replicación**.

Puede hacer un seguimiento del progreso del trabajo **Habilitar protección** en **Configuración** > **Trabajos** > **Trabajos de Site Recovery**. La máquina estará preparada para la conmutación por error después de que finalice el trabajo **Finalizar la protección**.

Para supervisar las máquinas virtuales que agregue, compruebe la última hora de detección de máquinas virtuales en **Servidores de configuración** > **Último contacto a las**. Para agregar máquinas virtuales sin esperar a la detección programada, resalte el servidor de configuración (no haga clic en él) y haga clic en **Actualizar**.

## <a name="next-steps"></a>pasos siguientes

> [!div class="nextstepaction"]
> [Exploración de la recuperación ante desastres](site-recovery-test-failover-to-azure.md)
