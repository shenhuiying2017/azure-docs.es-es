---
title: "Implementación del servidor de configuración para realizar la recuperación ante desastres de VMware con Azure Site Recovery | Microsoft Docs"
description: "En este artículo se describe cómo implementar un servidor de configuración para realizar la recuperación ante desastres de VMware con Azure Site Recovery"
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 02/25/2018
ms.author: anoopkv
ms.openlocfilehash: f7ec02cb4723d8a4ea0847810b1a1822cf20e957
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="deploy-a-configuration-server"></a>Implementar un servidor de configuración

Cuando se usa [Azure Site Recovery](site-recovery-overview.md) para realizar la recuperación ante desastres de servidores físicos y máquinas virtuales de VMware en Azure, se implementa un servidor de configuración local. El servidor de configuración coordina la comunicación entre Azure y VMware local. También administra la replicación de datos. Este artículo lo guiará por los pasos necesarios para implementar el servidor de configuración a la hora de replicar máquinas virtuales VMware en Azure. [Siga este artículo](site-recovery-set-up-physical-to-azure.md) si necesita configurar un servidor de configuración para la replicación del servidor físico.

## <a name="prerequisites"></a>requisitos previos

Se recomienda implementar el servidor de configuración como una máquina virtual de VMware de alta disponibilidad. En la tabla siguiente se resumen los requisitos mínimos de hardware.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]




## <a name="capacity-planning"></a>Planificación de capacidad

Los requisitos de tamaño para el servidor de configuración dependen de la tasa de cambio de datos potencial. Use esta tabla como guía.

| **CPU** | **Memoria** | **Tamaño del disco de caché** | **Frecuencia de cambio de datos** | **Máquinas protegidas** |
| --- | --- | --- | --- | --- |
| 8 vCPU (2 sockets * 4 núcleos @ 2,5 GHz) |16 GB |< 300 GB |500 GB o menos |Replicar menos de 100 máquinas. |
| 12 vCPUs (2 sockets * 6 núcleos @ 2,5 GHz) |18 GB |600 GB |500 GB a 1 TB |Replicar 100-150 máquinas. |
| 16 vCPUs (2 sockets * 8 núcleos @ 2,5 GHz) |32 GB |1 TB |1 TB a 2 TB |Replicar 150-200 máquinas. |


Si replica máquinas virtuales VMware, obtenga más información sobre las [consideraciones de planeación de capacidad](/site-recovery-plan-capacity-vmware.md). Ejecute la [herramienta Deployment Planner](site-recovery-deployment-planner.md) para la replicación de VMware.



## <a name="download-the-template"></a>Descarga de la plantilla

Site Recovery proporciona una plantilla que se puede descargar para configurar el servidor de configuración como una máquina virtual de VMware de alta disponibilidad. 

1. En el almacén, vaya a **Preparar infraestructura** > **Origen**.
2. En **Preparar origen**, seleccione **+Servidor de configuración**.
3. En **Agregar servidor**, compruebe que aparezca **Servidor de configuración para VMware** en **Tipo de servidor**.
4. Descargue la plantilla de Open Virtualization Format (OVF) para el servidor de configuración.

  > [!TIP]
  Puede descargar la versión más reciente de la plantilla del servidor de configuración directamente desde el [Centro de descarga de Microsoft](https://aka.ms/asrconfigurationserver).


## <a name="import-the-template-in-vmware"></a>Importación de la plantilla en VMware


1. Inicie sesión en el servidor VMware vCenter o el host vSphere ESXi con el cliente de VMWare vSphere.
2. En el menú **File** (Archivo), seleccione **Deploy OVF Template** (Implementar plantilla OVF) para iniciar el Asistente para implementar plantillas OVF.

     ![Plantilla de OVF](./media/tutorial-vmware-to-azure/vcenter-wizard.png)

3. En **Select source** (Seleccionar origen), especifique la ubicación de la plantilla OVF descargada.
4. En **Review details** (Revisar detalles), seleccione **Next** (Siguiente).
5. En **Select name and folder** (Seleccionar nombre y la carpeta) y **Select configuration** (Seleccionar configuración), acepte la configuración predeterminada.
6. En **Select storage** (Seleccionar almacenamiento), seleccione **Thick Provision Eager Zeroed** (Aprovisionamiento grueso diligente con ceros) en **Select virtual disk format** (Seleccionar formato de disco virtual) para el mejor rendimiento.
4. En el resto de las páginas del asistente, acepte la configuración predeterminada.
5. En **Ready to complete** (Listo para completar):

    * Para configurar la máquina virtual con la configuración predeterminada, seleccione **Power on after deployment** > **Finish** (Encender después de la implementación > Finalizar).

    * Para agregar una interfaz de red adicional, desactive **Power on after deployment** (Encender después de la implementación) y seleccione **Finish** (Finalizar). De forma predeterminada, la plantilla del servidor de configuración se implementa con una sola NIC. Puede agregar NIC adicionales después de la implementación.


## <a name="add-an-additional-adapter"></a>Incorporación de un adaptador adicional

Si desea agregar una NIC adicional al servidor de configuración, hágalo antes de registrar el servidor en el almacén. No se admiten adaptadores adicionales después del registro.

1. En el inventario de cliente de vSphere, haga clic con el botón derecho en la máquina virtual y seleccione **Editar configuración**.
2. En **Hardware**, seleccione **Agregar** > **Adaptador Ethernet**. Luego, seleccione **Siguiente**.
3. Seleccione el tipo de adaptador y la red. 
4. Para conectar la NIC virtual al encender la máquina virtual, seleccione **Connect at power on** (Conectar al encender). Después, seleccione **Siguiente** > **Finalizar** > **Aceptar**.
 

## <a name="register-the-configuration-server"></a>Registro del servidor de configuración 

1. Desde la consola de cliente de VMWare vSphere, encienda la máquina virtual.
2. La máquina virtual se inicia en una experiencia de instalación de Windows Server 2016. Acepte el contrato de licencia y especifique una contraseña de administrador.
3. Una vez finalizada la instalación, inicie sesión en la máquina virtual como administrador.
4. La primera vez que inicie sesión, se inicia la herramienta de configuración de Azure Site Recovery.
5. Especifique el nombre que se usará para registrar el servidor de configuración en Site Recovery. Luego, seleccione **Siguiente**.
6. La herramienta comprueba que la máquina virtual pueda conectarse a Azure. Una vez establecida la conexión, haga clic en **Iniciar sesión** para iniciar sesión en la suscripción de Azure. Las credenciales deben tener acceso al almacén donde desea registrar el servidor de configuración.
7. La herramienta realiza algunas tareas de configuración y, a continuación, se reinicia.
8. Inicie sesión de nuevo en la máquina. El Asistente para administración del servidor de configuración se inicia automáticamente.

### <a name="configure-settings"></a>Definición de la configuración

1. En el Asistente para administración del servidor de configuración, seleccione **Setup connectivity** (Conectividad de configuración). Seleccione la NIC para recibir tráfico de replicación y, luego, seleccione **Guardar**. Una vez configurado, este valor no se puede cambiar.
2. En **Seleccionar almacén de Recovery Services**, seleccione la suscripción de Azure y el grupo de recursos y almacén correspondientes.
3. En **Instalar software de terceros**, acepte el contrato de licencia. Seleccione **Descargar e instalar** para instalar MySQL Server.
4. Seleccione **Install VMware PowerCLI** (Instalar VMware PowerCLI). Asegúrese de que todas las ventanas del explorador están cerradas antes. Después, seleccione **Continuar**.
5. En **Validate appliance configuration** (Comprobar configuración del dispositivo), se comprueban los requisitos previos antes de continuar.
6. En **Configure vCenter Server/vSphere ESXi server** (Configurar vCenter Server/vSphere ESXi Server), especifique la dirección IP o FQDN del servidor vCenter o host de vSphere en los que se encuentran las máquinas virtuales donde desea realizar la replicación. Especifique el puerto de escucha del servidor y un nombre descriptivo para el servidor de VMware en el almacén.
7. Especifique las credenciales que usará el servidor de configuración para conectarse al servidor de VMware. Site Recovery usa estas credenciales para detectar automáticamente las máquinas virtuales de VMware disponibles para la replicación. Seleccione **Agregar** y, luego, **Continuar**.
8. En **Configure virtual machine credentials** (Configurar las credenciales de la máquina virtual), especifique el nombre de usuario y la contraseña que se usarán para instalar automáticamente el servicio Azure Site Recovery Mobility en las máquinas, cuando la replicación esté habilitada. Para las máquinas Windows, la cuenta necesita privilegios de administrador local en las máquinas que se vayan a replicar. Para Linux, proporcione los detalles de la cuenta raíz.
9. Seleccione **Finalize configuration** (Terminar configuración) para completar el registro. 
10. Cuando finalice el registro, en Azure Portal, verifique que el servidor de configuración y el servidor de VMware aparecen en la página **Origen** del almacén. Haga clic en **Aceptar** para configurar el destino.


## <a name="troubleshoot-deployment-issues"></a>Solución de problemas de implementación

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]



## <a name="next-steps"></a>pasos siguientes

Configure la recuperación ante desastres en Azure para [máquinas virtuales VMware](tutorial-vmware-to-azure.md).
